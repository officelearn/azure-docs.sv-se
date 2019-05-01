---
title: 'Självstudier: Azure Active Directory-integrering med närvaro hanteringstjänster | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och närvaro hanteringstjänster.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9449e196a59fa20288bca9d4401c5e56a97b86cd
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918399"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Självstudier: Azure Active Directory-integrering med närvaro hanteringstjänster

I den här självstudien får du lära dig hur du integrerar närvaro Management Services med Azure Active Directory (AD Azure).
Integrera närvaro Management Services med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till närvaro hanteringstjänster.
* Du kan aktivera användarna att vara automatiskt inloggad för närvaro Management Services (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med närvaro Management Services, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Närvaro hanteringstjänster enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för närvaro hanteringstjänster **SP** -initierad SSO

## <a name="adding-attendance-management-services-from-the-gallery"></a>Att lägga till närvaro hanteringstjänster från galleriet

För att konfigurera integrering av närvaro hanteringstjänster i Azure AD, som du behöver lägga till närvaro hanteringstjänster från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till närvaro hanteringstjänster från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **närvaro hanteringstjänster**väljer **närvaro hanteringstjänster** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Närvaro hanteringstjänster i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med närvaro hanteringstjänster baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i närvaro hanteringstjänster upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med närvaro Management Services, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera närvaro Management Services enkel inloggning](#configure-attendance-management-services-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa närvaro hanteringstjänster testanvändare](#create-attendance-management-services-test-user)**  – du har en motsvarighet för Britta Simon i närvaro hanteringstjänster som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med närvaro Management Services:

1. I den [Azure-portalen](https://portal.azure.com/)på den **närvaro hanteringstjänster** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Närvaro Management Services-domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [närvaro Management Services Client supportteamet](https://www.obcnet.jp/) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. På den **konfigurera närvaro hanteringstjänster** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-attendance-management-services-single-sign-on"></a>Konfigurera närvaro Management Services enkel inloggning

1. I ett annat webbläsarfönster inloggning till webbplatsen för företagets närvaro hanteringstjänster som administratör.

1. Klicka på **SAML-autentisering** under den **Security hanteringsavsnittet**.

    ![Närvaro Management Services-konfiguration](./media/attendancemanagementservices-tutorial/user1.png)

1. Utför följande steg:

    ![Närvaro Management Services-konfiguration](./media/attendancemanagementservices-tutorial/user2.png)

    a. Välj **Använd SAML-autentisering**.

    b. I den **identifierare** textrutan klistra in värdet för **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    c. I den **autentisering slutpunkts-URL** textrutan klistra in värdet för **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    d. Klicka på **Välj en fil** att ladda upp det certifikat som du har hämtat från Azure AD.

    e. Välj **inaktivera lösenordsautentisering**.

    f. Klicka på **registrering**

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till närvaro hanteringstjänster.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **närvaro hanteringstjänster**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **närvaro hanteringstjänster**.

    ![Länken närvaro hanteringstjänster i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-attendance-management-services-test-user"></a>Skapa närvaro hanteringstjänster testanvändare

Om du vill aktivera Azure AD-användare att logga in på närvaro Management Services, måste de etableras i närvaro hanteringstjänster. När det gäller närvaro Management Services är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på webbplatsen för företagets närvaro hanteringstjänster som administratör.

1. Klicka på **Användarhantering** under den **Security hanteringsavsnittet**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user5.png)

1. Klicka på **ny regler inloggning**.

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user3.png)

1. I den **OBCiD information** avsnittet, utför följande steg:

    ![Lägga till medarbetare](./media/attendancemanagementservices-tutorial/user4.png)

    a. I den **OBCiD** textrutan typ e-postmeddelandet av användare som `BrittaSimon\@contoso.com`.

    b. I textrutan **Password** (Lösenord) skriver du lösenordet för användaren.

    c. Klicka på **registrering**

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen närvaro hanteringstjänster i åtkomstpanelen, bör det vara loggas in automatiskt till närvaro Management-tjänster som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)